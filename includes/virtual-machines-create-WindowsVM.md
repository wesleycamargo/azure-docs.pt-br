1. Entre no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com). Confira a oferta [Avaliação Gratuita](http://www.windowsazure.com/pt-br/pricing/free-trial/) caso você ainda não tenha uma assinatura.

2. Na barra de comandos na parte inferior da janela, clique em **Novo**.

3. Em **Computação**, clique em **Máquina Virtual** e em **Da Galeria**.

	![Navigate to From Gallery in the Command Bar](./media/virtual-machines-create-WindowsVM/fromgallery.png)
	
4. A primeira tela permite **Escolher uma Imagem** para a máquina virtual em uma das listas na Galeria de Imagens. (As imagens disponíveis podem ser diferentes dependendo da assinatura que você estiver usando). Clique na seta para continuar.

	![Choose an image](./media/virtual-machines-create-WindowsVM/chooseimage.png)

5. A segunda tela permite separar um nome de computador, tamanho e nome e senha do usuário administrativo. Se você quiser apenas testar as Máquinas Virtuais do Azure, preencha os campos conforme mostrado na imagem abaixo. Do contrário, escolha a camada e o tamanho necessários à execução do aplicativo ou da carga de trabalho. Aqui estão alguns detalhes para ajudar você a preenchê-los: 
	
	- **Novo Nome de Usuário** refere-se à conta administrativa que você usa para gerenciar o servidor. Crie uma senha exclusiva para essa conta e não se esqueça dela. **Você precisará do nome do usuário e da senha para fazer logon na máquina virtual**. 

	- O tamanho de uma máquina virtual afeta o custo de usá-la, bem como as opções de configuração, como o número de discos de dados que é possível anexar. Para obter detalhes, consulte [Tamanhos de máquinas virtuais e serviços de nuvem do Azure](http://go.microsoft.com/fwlink/p/?LinkId=466520).

	![Configure the properties of the virtual machine](./media/virtual-machines-create-WindowsVM/vmconfiguration.png)



6. A terceira tela permite configurar recursos para rede, armazenamento e disponibilidade. Aqui estão alguns detalhes para ajudar você a preenchê-los: 
	

	- O **Nome DNS do Serviço de Nuvem** é o nome DNS global que se torna parte do URI usado no contato com a máquina virtual. Você precisará sugerir o próprio nome do serviço de nuvem porque ele deve ser exclusivo no Azure. Os serviços de nuvem são importantes para cenários que usem [várias máquinas virtuais](http://www.windowsazure.com/pt-br/documentation/articles/cloud-services-connect-virtual-machine/).
 
	- Para **Região/Grupo de Afinidade/Rede Virtual**, use uma região apropriada ao local. Também é possível optar por especificar uma rede virtual.
 
	>[AZURE.NOTE] Se você deseja que uma máquina virtual use uma rede virtual, você **deve** especificar a rede virtual ao criar a máquina virtual. Você não poderá adicionar a máquina virtual a uma rede virtual depois de criar a VM. Para obter mais informações, consulte [Visão geral da Rede Virtual do Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).

	- Para obter detalhes sobre como configurar pontos de extremidade, consulte [Como instalar pontos de extremidade em uma máquina virtual](http://www.windowsazure.com/pt-br/documentation/articles/virtual-machines-set-up-endpoints/).

	![Configure the connected resources of the virtual machine](./media/virtual-machines-create-WindowsVM/resourceconfiguration.png)

7. A quarta tela de configuração permite configurar o Agente de VM e algumas das extensões disponíveis. Clique na marca de seleção para criar a máquina virtual.


	![Configure VM Agent and extensions for the virtual machine](./media/virtual-machines-create-WindowsVM/agent-and-extensions.png)

	>[AZURE.NOTE] O agente de VM fornece o ambiente para instalar extensões que podem ajudar a interagir com ou gerenciar a máquina virtual. Para obter detalhes, consulte [Usando extensões](http://go.microsoft.com/FWLink/p/?LinkID=390493).  
    
8. Depois que a máquina virtual é criada, o Portal de Gerenciamento lista a nova máquina virtual em **Máquinas Virtuais**. O serviço de nuvem correspondente e a conta de armazenamento também são criados e listados nessas seções. Tanto a máquina virtual quanto o serviço de nuvem são iniciados automaticamente, e o Portal de Gerenciamento mostra o status como **Em Execução**. 

	![Configure VM Agent and the endpoints of the virtual machine](./media/virtual-machines-create-WindowsVM/vmcreated.png)


<!--HONumber=42-->
