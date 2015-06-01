1. Entre no [portal clássico](http://manage.windowsazure.com) do Azure. Confira a oferta [Avaliação Gratuita](http://azure.microsoft.com/pricing/free-trial/) caso você ainda não tenha uma assinatura.

2. Na barra de comandos na parte inferior da janela, clique em **Novo**.

3. Em **Computação**, clique em **Máquina Virtual** e em **Da Galeria**.

	![Navegar até Da Galeria na barra de comandos](./media/virtual-machines-create-WindowsVM/fromgallery.png)
	
4. A primeira tela permite **Escolher uma Imagem** para a máquina virtual em uma das listas na Galeria de Imagens. (As imagens disponíveis podem ser diferentes dependendo da assinatura que você estiver usando). Clique na seta para continuar.

	![Escolher uma imagem](./media/virtual-machines-create-WindowsVM/chooseimage.png)

5. A segunda tela permite separar um nome de computador, tamanho e nome e senha do usuário administrativo. Se você quiser apenas testar as Máquinas Virtuais do Azure, preencha os campos conforme mostrado na imagem abaixo. Do contrário, escolha a camada e o tamanho necessários à execução do aplicativo ou da carga de trabalho. Aqui estão alguns detalhes para ajudar você a preenchê-los:
	
	- **Novo Nome de Usuário** se refere à conta administrativa que você usa para gerenciar o servidor. Crie uma senha exclusiva para essa conta e não se esqueça dela. **Você precisará do nome do usuário e da senha para fazer logon na máquina virtual**. 

	- O tamanho de uma máquina virtual afeta o custo de usá-la, bem como as opções de configuração, como o número de discos de dados que é possível anexar. Para obter detalhes, consulte [Tamanhos de máquinas virtuais e serviço de nuvem do Azure](http://go.microsoft.com/fwlink/p/?LinkId=466520).

	![Configurar as propriedades da máquina virtual](./media/virtual-machines-create-WindowsVM/vmconfiguration.png)



6. A terceira tela permite configurar recursos para rede, armazenamento e disponibilidade. Aqui estão alguns detalhes para ajudar você a preenchê-los: 
	

	- The **Cloud Service DNS Name** is the global DNS name that becomes part of the URI that's used to contact the virtual machine. You'll need to come up with your own cloud service name because it must be unique in Azure. Cloud services are important for scenarios using [multiple virtual machines](../articles/cloud-services-connect-virtual-machine.md).
 
	- For **Region/Affinity Group/Virtual Network**, use a region that's appropriate to your location. You can also choose to specify a virtual network instead.
 
	>[AZURE.NOTE] If you want a virtual machine to use a virtual network, you **must** specify the virtual network when you create the virtual machine. You can't join the virtual machine to a virtual network after you create the VM. For more information, see [Azure Virtual Network Overview](http://go.microsoft.com/fwlink/p/?LinkID=294063).

	- For details about configuring endpoints, see [How to Set Up Endpoints to a Virtual Machine](../articles/virtual-machines-set-up-endpoints-classic-portal.md).

	![Configure the connected resources of the virtual machine](./media/virtual-machines-create-WindowsVM/resourceconfiguration.png)

7. A quarta tela de configuração permite configurar o Agente de VM e algumas das extensões disponíveis. Clique na marca de seleção para criar a máquina virtual.


	![Configure VM Agent and extensions for the virtual machine](./media/virtual-machines-create-WindowsVM/agent-and-extensions.png)

	>[AZURE.NOTE] The VM agent provides the environment for you to install extensions that can help you interact with or manage the virtual machine. For details, see [Using Extensions](http://go.microsoft.com/FWLink/p/?LinkID=390493).  
    
8. Depois que a máquina virtual é criada, o portal clássico lista a nova máquina virtual em **Máquinas Virtuais**. O serviço de nuvem correspondente e a conta de armazenamento também são criados e listados nessas seções. A máquina virtual e o serviço de nuvem são iniciados automaticamente, e seu status é listado como **Em Execução**. 

	![Configurar Agente de VM e os pontos de extremidade da máquina virtual](./media/virtual-machines-create-WindowsVM/vmcreated.png) <!--HONumber=52-->
