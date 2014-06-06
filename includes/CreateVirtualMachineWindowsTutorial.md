# Criar uma máquina virtual executando o Windows Server #

É fácil criar uma máquina virtual que executa o sistema operacional Windows Server quando você usa a Galeria de Imagens no Portal de Gerenciamento do Azure. Este tutorial ensina como criar uma máquina virtual com o Windows Server na nuvem para que você possa acessar e personalizar. Não é necessário experiência anterior com o Azure para usar este tutorial. 

Você aprenderá a:

- [Sobre máquinas virtuais no Azure] []
- [Como criar a máquina virtual] []
- [Como fazer o logon na máquina virtual após você criá-la] []
- [Como anexar um disco de dados à nova máquina virtual] []

**Observação**: Este tutorial cria uma máquina virtual que não está conectada a uma rede virtual.  Se você deseja que uma máquina virtual use uma rede virtual, você deve especificar a rede virtual ao criar a máquina virtual. Para obter mais informações sobre redes virtuais, consulte [Visão geral da rede virtual do Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063)

##<a id="virtualmachine"> </a>Sobre máquinas virtuais no Azure ##

A máquina virtual no Azure é um servidor na nuvem que pode ser controlado e gerenciado. Depois de criar uma máquina virtual no Azure, você pode acessá-la como qualquer outro servidor, além de excluí-la e recriá-la sempre que precisar. Para aprender mais, consulte [Máquinas Virtuais](http://go.microsoft.com/fwlink/p/?LinkID=271224).

##<a id="custommachine"> </a>Como criar a máquina virtual##

Este tutorial mostra como usar o **da galeria** método no Portal de gerenciamento para criar uma máquina virtual personalizada. Este método fornece mais opções do que o método **Criação Rápida** para configurar uma máquina virtual quando você criá-la.


1. Entre no [Portal de Gerenciamento](http://manage.windowsazure.com) e navegue em seu site. Se você não tiver uma assinatura, poderá inscrever-se para uma [avaliação gratuita](http://go.microsoft.com/fwlink/p/?LinkID=23435).

2. Na barra de comandos, clique em **Novo**.

3. Clique em **Máquina Virtual** e, em seguida, clique em **Da Galeria**.
	
4. De **Escolher uma Imagem**, selecione **Datacenter do Windows Server 2012 R2** (As imagens disponíveis podem ser diferentes dependendo da assinatura que você estiver usando). Clique na seta para continuar.
	
5. Se várias versões da imagem estiverem disponíveis, na **Data de lançamento da versão**, selecione a versão que deseja usar.

6. Em **Nome da Máquina Virtual**, digite o nome que você deseja usar para a máquina virtual. Para esta máquina virtual, digite **MyTestVM1**.

7. Em **Tamanho**, selecione o tamanho da máquina virtual. O tamanho que você deveria selecionar depende do número de núcleos que são necessários para o seu aplicativo. Para esta máquina virtual, escolha o menor tamanho disponível.

8. Em **Novo Nome de Usuário**, digite um nome para a conta administrativa que você deseja usar para gerenciar o servidor. Para esta máquina virtual, digite **MyTestVM1Admin**.

9. Em **Nova Senha**, digite uma senha forte para a conta administrativa da máquina virtual. Em **Confirmar Senha**, digite a senha novamente. Clique na seta para continuar.

10. Você pode colocar máquinas virtuais juntas em um serviço de nuvem para fornecer aplicações robustas, mas para este tutorial, você cria somente uma única máquina virtual. Para fazer isso, selecione **Criar um novo serviço de nuvem**.

11. Em **Nome DNS de serviço de nuvem**, digite um nome que usa entre 3 e 24 letras minúsculas e números. Esse nome se torna parte do URI que é usado para entrar em contato com a máquina virtual por meio do serviço de nuvem. Para esta máquina virtual, digite **MyService1**.

12. Em **Região/Grupo de Afinidade/Rede Virtual**, selecione a região onde você deseja localizar a máquina virtual.

13. Para armazenar o VHD que usará a máquina virtual, aceite a configuração padrão de **Usar uma conta de armazenamento gerada automaticamente**.

14. Em **Conjunto de disponibilidade**, para os fins deste tutorial, usa-se a configuração padrão **Nenhum**. Clique na seta para continuar.

15.  Em **Agente de VM**, decida se deseja instalar o agente de VM. Este agente fornece o ambiente para a instalação de extensões que podem ajudá-lo a interagir com a máquina virtual. Para obter detalhes, consulte [Usando extensões](http://go.microsoft.com/FWLink/p/?LinkID=390493).  
 
16. Em **Pontos de extremidade**, examine os novos pontos de extremidade que serão criados para permitir conexões para a máquina virtual, como a área de trabalho remota ou Windows PowerShell. Você pode também adicionar mais pontos de extremidade agora ou criá-los mais tarde. Para obter instruções sobre como criá-los mais tarde, consulte [Como definir a comunicação com uma máquina virtual](http://www.windowsazure.com/pt-br/manage/linux/how-to-guides/setup-endpoints/).

17. Clique na marca de seleção para criar a máquina virtual.
    
	Após a criação da máquina virtual e do serviço de nuvem, o Portal de gerenciamento lista a nova máquina virtual em **Máquinas Virtuais** e lista o serviço de nuvem em **Serviços de Nuvem**. A máquina virtual e o serviço de nuvem são iniciados automaticamente.


## <a id="logon"> </a>Como fazer o logon na máquina virtual após você criá-la ##

Você pode fazer logon na máquina virtual que você criou para gerenciar as configurações e os aplicativos que estão em execução.

1. Entre no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com).

2. Clique em **Máquinas Virtuais** e, em seguida, selecione a máquina virtual **MyTestVM1**.

3. Na barra de comandos, clique em **Conectar**.

4. Clique em **Abrir** para usar o arquivo de protocolo de área de trabalho remoto que foi criado automaticamente para a máquina virtual.

5. Clique em **Conectar**.

	![Continuar com a conexão](./media/CreateVirtualMachineWindowsTutorial/connectpublisher.png)

6. Na caixa senha, digite o nome de usuário e a senha que você especificou quando criou a máquina virtual e, em seguida, clique em **OK**.

7. Clique em **Sim** para verificar a identidade da máquina virtual.

	![Verificar a identidade do computador](./media/CreateVirtualMachineWindowsTutorial/connectverify.png)

	Você agora pode trabalhar com a máquina virtual, como faria com qualquer outro servidor.

## <a id="attachdisk"> </a>Como anexar um disco de dados à nova máquina virtual ##

Seu aplicativo pode precisar armazenar dados. Para configurar como anexar um disco de dados à nova máquina virtual. A maneira mais fácil de fazer isso é anexar um disco de dados vazio à máquina virtual.

1. Entre no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com).

2. Clique em **Máquinas Virtuais** e, em seguida, selecione a máquina virtual **MyTestVM1**.

3. Na barra de comandos, clique em **Anexar** e, em seguida, clique em **Anexar Disco Vazio**.

	A caixa de diálogo **Anexar Disco Vazio** é exibida.

	> [WACOM.NOTE] A página de início rápido pode ser exibida em vez da barra de comando e painel de controle. Se isso acontecer, clique em **Painel** na parte superior.

4. O **Nome da Máquina Virtual**, **Local de Armazenamento**, **Nome do Arquivo** e **Preferência de Cache do Host** já estão definidos para você. Tudo o que você precisa fazer é digitar o tamanho desejado para o disco. Digite **5** no campo **Tamanho**.

	**Observação:** todos os discos são criados a partir de um arquivo VHD no armazenamento do Azure. Você pode fornecer um nome para o arquivo VHD que é adicionado ao armazenamento, mas o Azure gera o nome do disco automaticamente.

5. Clique na marca de seleção para anexar o disco de dados à máquina virtual.

6. Clique no nome da máquina virtual. Isso exibe o painel, assim você pode verificar se o disco de dados foi anexado com êxito à máquina virtual, observando o painel.

	A máquina virtual tem agora 2 discos. O disco que você anexou está listado na tabela **Discos**.

	![Anexar disco vazio](./media/CreateVirtualMachineWindowsTutorial/attachemptysuccess.png)

	Depois de anexar os dados do disco à máquina virtual, o disco estará offline e não inicializado. Antes de você poder usar os dados armazenados, você precisa fazer logon na máquina virtual e inicializar o disco.

7. Conectar-se à máquina virtual usando as etapas listadas na seção anterior, **Como fazer logon na máquina virtual depois de criá-la**.

8. Depois de fazer logon na máquina virtual, abra o **Gerenciador de Servidores**. No painel esquerdo, expanda **Armazenamento**e, em seguida, clique em **Gerenciamento de Disco**.

	![Inicializar o disco no Gerenciador de servidores](./media/CreateVirtualMachineWindowsTutorial/servermanager.png)

9. Clique com o botão direito em **Disco 2** e, em seguida, clique em **Inicializar disco**.

	![Começar inicialização](./media/CreateVirtualMachineWindowsTutorial/initializedisk.png)

10. Clique em **OK** para iniciar o processo de inicialização.

11. Com o botão direito do mouse na área de alocação de espaço para o Disco 2, clique em **Novo Volume Simples** e, em seguida, conclua o assistente com os valores padrão.

	![Criar o volume](./media/CreateVirtualMachineWindowsTutorial/initializediskvolume.png)

	O disco está online e pronto para ser usado com uma nova letra de unidade.

	![Inicialização bem-sucedida](./media/CreateVirtualMachineWindowsTutorial/initializesuccess.png)


##Próximas etapas 

Para saber mais sobre a configuração de máquinas virtuais do Windows no Azure, consulte os seguintes artigos:

-[Como conectar máquinas virtuais em um serviço de nuvem](http://www.windowsazure.com/pt-br/documentation/articles/cloud-services-connect-virtual-machine/)

-[Gerenciar a disponibilidade de máquinas virtuais](http://www.windowsazure.com/pt-br/documentation/articles/manage-availability-virtual-machines/)

[Sobre máquinas virtuais no Azure]: #virtualmachine
[Como criar a máquina virtual]: #custommachine
[Como fazer o logon na máquina virtual após você criá-la]: #logon
[Como anexar um disco de dados à nova máquina virtual]: #attachdisk
[Como configurar a comunicação com a máquina virtual]: #endpoints

