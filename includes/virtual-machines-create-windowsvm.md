1. Entre no [portal clássico](http://manage.windowsazure.com). 
2. Na barra de comandos na parte inferior da janela, clique em **Novo**.
3. Em **Computação**, clique em **Máquina Virtual** e em **Da Galeria**.
   
    ![Navegar até Da Galeria na barra de comandos](./media/virtual-machines-create-WindowsVM/fromgallery.png)
4. A primeira tela depois dessa permite que você **Escolha uma Imagem** para sua máquina virtual na lista de imagens disponíveis. Você pode escolher uma imagem da galeria ou selecione imagens e discos que você carregou. As imagens disponíveis podem ser diferentes dependendo da assinatura que você estiver usando.
5. A segunda tela permite separar um nome de computador, tamanho e nome e senha do usuário administrativo. Use a camada e o tamanho necessários à execução do aplicativo ou da carga de trabalho. Aqui estão algumas dicas:
   
   * **Nome da Máquina Virtual** pode conter apenas letras, números e hifens. Ele também deve começar com uma letra ou número e terminar com uma letra ou número.
   * **Novo Nome de Usuário** se refere à conta administrativa que você usa para gerenciar o servidor. A senha deve ter entre 8 e 123 caracteres e pelo menos três dos seguintes: um caractere minúsculo, um caractere maiúsculo, um número e um caractere especial. **Você precisará do nome de usuário e da senha para conectar e fazer logon na máquina virtual**.
   * O tamanho de uma máquina virtual afeta o custo de usá-la, bem como as opções de configuração, como quantos discos de dados é possível anexar. Para obter mais informações, confira [Tamanhos das máquinas virtuais](../articles/virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
6. A terceira tela permite configurar recursos para rede, armazenamento e disponibilidade. Aqui estão algumas dicas:
   
   * O **Nome DNS do Serviço de Nuvem** é o nome DNS global que se torna parte do URI usado no contato com a máquina virtual. Você precisará sugerir o próprio nome do serviço de nuvem porque ele deve ser exclusivo no Azure. Os serviços de nuvem são importantes para cenários que usem [várias máquinas virtuais](../articles/virtual-machines/virtual-machines-windows-classic-connect-vms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
   * Para **Região/Grupo de Afinidade/Rede Virtual**, use uma região apropriada ao local. Também é possível optar por especificar uma rede virtual.
   * Se você desejar que uma máquina virtual use uma rede virtual, **deverá** especificar a rede virtual ao criar a máquina virtual. Você não poderá adicionar a máquina virtual a uma rede virtual depois de criar a VM. Para saber mais, consulte [Visão geral da Rede Virtual do Azure](../articles/virtual-network/virtual-networks-overview.md).
   * Para obter detalhes sobre como configurar pontos de extremidade, consulte [Como instalar pontos de extremidade em uma máquina virtual](../articles/virtual-machines/virtual-machines-windows-classic-setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
7. A quarta tela de configuração permite instalar o Agente de VM e configurar algumas das extensões disponíveis.
   
   > [!NOTE]
   > O agente de VM fornece o ambiente para instalar extensões que podem ajudar a interagir com ou gerenciar a máquina virtual. Para obter detalhes, confira [Sobre o agente da VM e extensões](../articles/virtual-machines/virtual-machines-windows-classic-agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).  
   > 
   > 
8. Depois que a máquina virtual é criada, o portal clássico lista a nova máquina virtual em **Máquinas Virtuais**. O serviço de nuvem correspondente e a conta de armazenamento também são criados e listados nessas seções. A máquina virtual e o serviço de nuvem são iniciados automaticamente, e seu status é listado como **Em Execução**.
   
    ![Configurar Agente de VM e os pontos de extremidade da máquina virtual](./media/virtual-machines-create-WindowsVM/vmcreated.png)



<!--HONumber=Nov16_HO4-->


