1.  Entre no [Portal de Gerenciamento][Portal de Gerenciamento] do Azure. Confira a oferta [Avaliação Gratuita][Avaliação Gratuita] caso você ainda não tenha uma assinatura.

2.  Na barra de comandos na parte inferior da janela, clique em **Novo**.

3.  Em **Computação**, clique em **Máquina Virtual** e em **Da Galeria**.

    ![Navegar até Da Galeria na barra de comandos][Navegar até Da Galeria na barra de comandos]

4.  A primeira tela permite **Escolher uma Imagem** para a máquina virtual em uma das listas na Galeria de Imagens. (As imagens disponíveis podem ser diferentes dependendo da assinatura que você estiver usando). Clique na seta para continuar.

    ![Escolher uma imagem][Escolher uma imagem]

5.  A segunda tela permite separar um nome de computador, tamanho e nome e senha do usuário administrativo. Se você quiser apenas testar as Máquinas Virtuais do Azure, preencha os campos conforme mostrado na imagem abaixo. Do contrário, escolha a camada e o tamanho necessários à execução do aplicativo ou da carga de trabalho. Aqui estão alguns detalhes para ajudar você a preenchê-los:

    -   **Novo Nome de Usuário** se refere à conta administrativa que você usa para gerenciar o servidor. Crie uma senha exclusiva para essa conta e não se esqueça dela. **Você precisará do nome do usuário e da senha para fazer logon na máquina virtual**.

    -   O tamanho de uma máquina virtual afeta o custo de usá-la, bem como as opções de configuração, como o número de discos de dados que é possível anexar. Para obter detalhes, consulte [Tamanhos de máquinas virtuais e serviço de nuvem do Azure][Tamanhos de máquinas virtuais e serviço de nuvem do Azure].

    ![Configurar as propriedades da máquina virtual][Configurar as propriedades da máquina virtual]

6.  A terceira tela permite configurar recursos para rede, armazenamento e disponibilidade. Aqui estão alguns detalhes para ajudar você a preenchê-los:

    -   O **Nome DNS do Serviço de Nuvem** é o nome DNS global que se torna parte do URI usado no contato com a máquina virtual. Você precisará sugerir o próprio nome do serviço de nuvem porque ele deve ser exclusivo no Azure. Os serviços de nuvem são importantes para cenários que usem [várias máquinas virtuais][várias máquinas virtuais].

    -   Para **Região/Grupo de Afinidade/Rede Virtual**, use uma região apropriada ao local. Também é possível optar por especificar uma rede virtual.

    > [WACOM.NOTE] Se quiser que uma máquina virtual use uma máquina virtual, você **deverá** especificar a rede virtual ao criar a máquina virtual. Você não poderá inserir a máquina virtual em uma rede virtual depois de criar a VM. Para obter mais informações, consulte [Visão geral da Rede Virtual do Azure][Visão geral da Rede Virtual do Azure].

    -   Para obter detalhes sobre como configurar pontos de extremidade, consulte [Como instalar pontos de extremidade em uma máquina virtual][Como instalar pontos de extremidade em uma máquina virtual].

    ![Configurar os recursos conectados da máquina virtual][Configurar os recursos conectados da máquina virtual]

7.  A quarta tela de configuração permite configurar o Agente de VM e algumas das extensões disponíveis. Clique na marca de seleção para criar a máquina virtual.

    ![Configurar Agente de VM e extensões da máquina virtual][Configurar Agente de VM e extensões da máquina virtual]

    > [WACOM.NOTE] O agente de VM fornece o ambiente para instalar extensões que podem ajudar a interagir com ou gerenciar a máquina virtual. Para obter detalhes, consulte [Usando extensões][Usando extensões].

8.  Depois que a máquina virtual é criada, o Portal de Gerenciamento lista a nova máquina virtual em **Máquinas Virtuais**. O serviço de nuvem correspondente e a conta de armazenamento também são criados e listados nessas seções. A máquina virtual e o serviço de nuvem são iniciados automaticamente, e o Portal de Gerenciamento mostra o status como **Em Execução**.

    ![Configurar Agente de VM e os pontos de extremidade da máquina virtual][Configurar Agente de VM e os pontos de extremidade da máquina virtual]

  [Portal de Gerenciamento]: http://manage.windowsazure.com
  [Avaliação Gratuita]: http://www.windowsazure.com/en-us/pricing/free-trial/
  [Navegar até Da Galeria na barra de comandos]: ./media/virtual-machines-create-WindowsVM/fromgallery.png
  [Escolher uma imagem]: ./media/virtual-machines-create-WindowsVM/chooseimage.png
  [Tamanhos de máquinas virtuais e serviço de nuvem do Azure]: http://go.microsoft.com/fwlink/p/?LinkId=466520
  [Configurar as propriedades da máquina virtual]: ./media/virtual-machines-create-WindowsVM/vmconfiguration.png
  [várias máquinas virtuais]: http://www.windowsazure.com/pt-br/documentation/articles/cloud-services-connect-virtual-machine/
  [Visão geral da Rede Virtual do Azure]: http://go.microsoft.com/fwlink/p/?LinkID=294063
  [Como instalar pontos de extremidade em uma máquina virtual]: http://www.windowsazure.com/pt-br/documentation/articles/virtual-machines-set-up-endpoints/
  [Configurar os recursos conectados da máquina virtual]: ./media/virtual-machines-create-WindowsVM/resourceconfiguration.png
  [Configurar Agente de VM e extensões da máquina virtual]: ./media/virtual-machines-create-WindowsVM/agent-and-extensions.png
  [Usando extensões]: http://go.microsoft.com/FWLink/p/?LinkID=390493
  [Configurar Agente de VM e os pontos de extremidade da máquina virtual]: ./media/virtual-machines-create-WindowsVM/vmcreated.png
